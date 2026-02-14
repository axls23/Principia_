# Design Specification: Principia MVP

## Overview

Principia is an AI learning partner that teaches first-principles thinking through structured scaffolding. The MVP focuses on solving critical learning challenges faced by university students and mid-career professionals.

### Target Learning Challenges

**University to Mid-Career Learners Face**:
1. **Surface-Level Understanding**: Memorizing without grasping principles → Principia enforces reasoning through assumptions and dependencies
2. **Passive Learning Dependency**: Relying on direct answers → Principia makes you think through Bloom's Taxonomy progression
3. **Poor Knowledge Retention**: Forgetting after exams/projects → Principia uses spaced repetition with decay tracking
4. **Transfer Failure**: Can't apply to new situations → Gear 2 (Apply) focuses on novel contexts
5. **Weak Critical Thinking**: Difficulty analyzing/evaluating → Gears 3-4 develop higher-order thinking
6. **Hidden Assumptions**: Unexamined beliefs → Assumption discovery challenges implicit assumptions
7. **Fragmented Knowledge**: Isolated concepts → Knowledge map builds connected understanding
8. **No Process Feedback**: Only final answer feedback → Reflection checks evaluate reasoning quality

### Solution Approach

Principia provides an interactive learning workspace (NOT a chatbot) with:
- Enforced engagement through structured reasoning steps
- Bloom's Taxonomy-based cognitive development (Understand → Apply → Analyze → Evaluate/Create)
- Spaced repetition for long-term retention
- Visual knowledge maps showing concept connections
- Reflection checks that evaluate thinking process, not just answers
- Audio-visual whiteboard for natural, Khan Academy-style learning

### Architecture Statistics: Rule-Based vs. Reasoning Models

**Static/Rule-Based Components (≈60% of system)**:
1. **Scaffolding Controller** - Rule-based gear selection, scaffolding configuration
2. **Assumption Finder** - Pattern matching for assumption detection
3. **Knowledge Tracker** - Mathematical decay formula, review scheduling
4. **Reflection Validator** - Structured evaluation criteria
5. **Whiteboard Controller** - Deterministic visual generation, animation timing
6. **Audio-Visual Sync** - Timeline coordination, sync point calculation
7. **State Management** - Data persistence, session management

**Reasoning Model Components (≈40% of system)**:
1. **Response Builder** - LLM generates explanations, hints, questions
2. **Concept Library Matching** - LLM identifies domain from user question
3. **Reflection Evaluation** - LLM evaluates user explanations for accuracy/completeness
4. **Visual Content Generation** - LLM generates diagram descriptions
5. **Audio Script Generation** - LLM creates natural teaching dialogue
6. **Feedback Generation** - LLM provides specific, effort-based feedback

**Why This Balance?**:
- **Predictability**: Rule-based components ensure consistent learning psychology principles
- **Reliability**: Mathematical formulas (decay, gear selection) are deterministic
- **Cost**: Rule-based components are cheaper to run at scale
- **Quality**: LLMs handle creative tasks (explanations, feedback) where flexibility is needed
- **Testability**: Rule-based components are easier to test with property-based testing

**Hybrid Approach Benefits**:
- Gear selection uses rules (accuracy thresholds) but LLM generates gear-appropriate content
- Assumption detection uses patterns but LLM generates Socratic questions
- Knowledge tracking uses formulas but LLM generates review prompts
- Whiteboard uses deterministic rendering but LLM generates visual concepts

### Core Design Philosophy

**NOT a Chatbot - A Learning Partner**:

Principia is fundamentally different from a chatbot in these ways:

1. **Enforced Engagement**: You cannot get answers without working through reasoning steps. The system blocks direct answers until you engage with assumptions and reasoning paths.

2. **Structured Workspace**: Instead of a chat stream, you work in a structured reasoning workspace with:
   - Progress tracker showing which reasoning step you're on (1/4, 2/4, etc.)
   - Assumption challenges that must be addressed before proceeding
   - Interactive scaffolding that adapts to your gear level
   - Visual knowledge map showing what you've learned and what needs review

3. **Higher-Order Thinking (Bloom's Taxonomy)**: The system develops your cognitive capabilities through four levels:
   - **Gear 1 (Understand)**: Grasp concepts and relationships with scaffolding - multiple choice, immediate feedback
   - **Gear 2 (Apply)**: Use concepts in new situations with support - application scenarios, hints on request
   - **Gear 3 (Analyze)**: Break down concepts and examine relationships independently - comparison, pattern analysis
   - **Gear 4 (Evaluate/Create)**: Critical judgment and synthesis - evaluate reasoning, design solutions, create connections

4. **Active Learning**: The system makes YOU do the thinking:
   - Gear 1: Multiple choice questions force you to evaluate options and understand relationships
   - Gear 2: Application scenarios where you use concepts in new contexts
   - Gear 3: Analysis tasks where you break down and compare approaches
   - Gear 4: Evaluation and creation tasks where you critique and design solutions

4. **Memory & Progress**: Unlike chatbots that forget:
   - Tracks what you understand and when you need review
   - Builds a concept map of your knowledge
   - Adapts cognitive difficulty based on Bloom's Taxonomy levels
   - Celebrates milestones and shows progress through cognitive development

5. **Learning Psychology**: Built on evidence-based learning principles:
   - Bloom's Taxonomy for cognitive development (Understand → Apply → Analyze → Evaluate/Create)
   - Spaced repetition for long-term retention
   - Retrieval practice through reflection checks
   - Graduated scaffolding that fades as you improve
   - Effort-based feedback that builds metacognition

**Practical Learning Mechanics**:
- **Learning Gears (Bloom's Taxonomy)**: Four levels aligned with higher-order thinking - Understand, Apply, Analyze, Evaluate/Create
- **Knowledge Tracker**: Simple decay-based system for spaced review
- **Reflection Checks**: Periodic validation through explanation
- **Scaffolding Fade**: Gradual reduction of support as cognitive capability grows

**Implementation Priorities**:
1. Interactive learning workspace with visual whiteboard
2. Audio interaction with visual explanations (Khan Academy style)
3. Core scaffolding mechanics with enforced engagement
4. Simple knowledge tracking with visual feedback
5. Basic reasoning validation (phase 2)

## Architecture

### High-Level System Context

```
┌─────────────────────────────────────────────────────────────────┐
│                      User Interfaces                            │
│  ┌──────────────────┐              ┌──────────────────┐        │
│  │  Audio Input     │              │   Whiteboard     │        │
│  │  (Voice/Speech)  │              │   Canvas         │        │
│  └────────┬─────────┘              └────────┬─────────┘        │
└───────────┼──────────────────────────────────┼──────────────────┘
            │                                  │
            └──────────────┬───────────────────┘
                           │
┌──────────────────────────▼───────────────────────────────────────┐
│                    Reasoning Engine                               │
│  ┌──────────────────────────────────────────────────────────────┐│
│  │              Scaffolding Controller                          ││
│  │  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌──────────┐ ││
│  │  │ Assumption │ │  Concept   │ │ Knowledge  │ │ Response │ ││
│  │  │ Finder     │→│  Library   │→│ Tracker    │→│ Builder  │ ││
│  │  └────────────┘ └────────────┘ └────────────┘ └──────────┘ ││
│  └──────────────────────────────────────────────────────────────┘│
│                                                                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │    Gear      │  │  Reflection  │  │  Whiteboard  │          │
│  │   Manager    │  │  Validator   │  │  Controller  │          │
│  │  (Bloom's)   │  │              │  │  (Visual)    │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└───────────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────▼─────────────────────────────────────┐
│                  Audio-Visual Presentation Layer                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│  │    Audio     │  │   Visual     │  │     Sync     │           │
│  │  Generator   │  │  Renderer    │  │  Controller  │           │
│  │  (TTS)       │  │  (Canvas)    │  │  (Timeline)  │           │
│  └──────────────┘  └──────────────┘  └──────────────┘           │
└───────────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────▼─────────────────────────────────────┐
│                      State Management                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│  │   Session    │  │  Knowledge   │  │   Learning   │           │
│  │   Context    │  │   Map        │  │   Metrics    │           │
│  └──────────────┘  └──────────────┘  └──────────────┘           │
└───────────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────▼─────────────────────────────────────┐
│          Reinforcement Learning Layer (Phase 2/3)                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│  │  RL Agent    │  │   Student    │  │    Safety    │           │
│  │  (Policy)    │  │   Model      │  │    Layer     │           │
│  │  PPO          │  │  (Adaptive)  │  │  (Override)  │           │
│  └──────────────┘  └──────────────┘  └──────────────┘           │
│                                                                   │
│  Learns optimal teaching strategies from user interactions       │
│  State: 30-50 features | Actions: 50-60 pedagogical moves       │
│  Rewards: Learning gains + Engagement + Retention                │
└───────────────────────────────────────────────────────────────────┘
```

### Component Flow

```
User Audio Input
     │
     ▼
┌─────────────────────┐
│  Speech-to-Text     │
│  Processor          │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Input Processor    │
│  • Parse question   │
│  • Detect domain    │
│  • Check history    │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Scaffolding         │
│ Controller          │
│  • Select framework │
│  • Determine gear   │
│  • Plan reflections │
│  • Plan visuals     │
└──────────┬──────────┘
           │
    ┌──────┼──────┬──────────┐
    ▼      ▼      ▼          ▼
┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
│Assump- │ │Concept │ │Knowl-  │ │White-  │
│tion    │ │Library │ │edge    │ │board   │
│Finder  │ │        │ │Tracker │ │Ctrl    │
└───┬────┘ └───┬────┘ └───┬────┘ └───┬────┘
    │          │          │          │
    └──────────┼──────────┼──────────┘
               │          │
               ▼          ▼
┌──────────────────────────┐  ┌──────────────────────┐
│   Response Builder       │  │  Visual Generator    │
│  • Format for gear       │  │  • Create diagrams   │
│  • Add interactions      │  │  • Plan animations   │
│  • Generate audio script │  │  • Sync with audio   │
└──────────┬───────────────┘  └──────────┬───────────┘
           │                             │
           └──────────┬──────────────────┘
                      ▼
┌──────────────────────────────────────────┐
│   Audio-Visual Synchronizer              │
│  • Sync audio with whiteboard            │
│  • Coordinate timing                     │
│  • Handle user interactions              │
└──────────┬───────────────────────────────┘
           │
    ┌──────┴──────┐
    ▼             ▼
┌─────────┐  ┌─────────────┐
│  Audio  │  │  Whiteboard │
│  Output │  │  Rendering  │
│  (TTS)  │  │  (Canvas)   │
└─────────┘  └─────────────┘
```

## Components and Interfaces

### 1. Scaffolding Controller

**Responsibility**: Orchestrates the learning experience by selecting appropriate scaffolding based on user's current state.

**Interface**:
```typescript
interface ScaffoldingController {
  processQuestion(input: string, context: SessionContext): ScaffoldingPlan;
  selectGear(context: SessionContext): LearningGear;
  shouldTriggerReflection(context: SessionContext): boolean;
  updateScaffoldingTrace(used: string[], bypassed: string[]): void;
}

type ScaffoldingPlan = {
  frameworkId: string;
  assumptionPrompts: string[];
  guidingQuestions: string[];
  hints: string[];
  reflectionPrompt?: string;
  estimatedSteps: number;
}

enum LearningGear {
  GEAR_1_UNDERSTAND = 1,    // Bloom's: Understand - grasp concepts and relationships
  GEAR_2_APPLY = 2,         // Bloom's: Apply - use concepts in new situations
  GEAR_3_ANALYZE = 3,       // Bloom's: Analyze - break down and examine relationships
  GEAR_4_EVALUATE_CREATE = 4 // Bloom's: Evaluate/Create - critical judgment and synthesis
}
```

**Gear Selection Logic**:
```typescript
function selectGear(context: SessionContext): LearningGear {
  // Check for explicit user request
  if (context.userRequestedGear) {
    return context.userRequestedGear;
  }
  
  // Calculate recent accuracy (last 5 interactions)
  const recentAccuracy = calculateAccuracy(context.recentReflections);
  
  // Calculate response fluency (inverse of time taken)
  const fluency = calculateFluency(context.recentResponseTimes);
  
  // Composite score (accuracy weighted more heavily)
  const score = (recentAccuracy * 0.7) + (fluency * 0.3);
  
  // Apply hysteresis to prevent oscillation
  const currentGear = context.currentGear;
  const threshold = 0.1; // 10% margin required for change
  
  if (score > 0.8 + threshold && currentGear < LearningGear.GEAR_4_ADVANCED) {
    return currentGear + 1;
  } else if (score < 0.5 - threshold && currentGear > LearningGear.GEAR_1_GUIDED) {
    return currentGear - 1;
  }
  
  return currentGear;
}
```

**Scaffolding Configuration by Gear (Bloom's Taxonomy)**:
```typescript
function getScaffoldingConfig(gear: LearningGear): ScaffoldingConfig {
  switch (gear) {
    case LearningGear.GEAR_1_UNDERSTAND:
      // Bloom's: Understand - Comprehension with scaffolding
      return {
        cognitiveLevel: 'understand',
        questionType: 'multiple_choice',
        hintsAvailable: 'immediate',
        feedbackTiming: 'immediate',
        scaffoldDensity: 'high',
        allowSkip: false,
        focusVerbs: ['explain', 'describe', 'identify', 'recognize']
      };
    
    case LearningGear.GEAR_2_APPLY:
      // Bloom's: Apply - Use concepts in new situations
      return {
        cognitiveLevel: 'apply',
        questionType: 'open_with_hints',
        hintsAvailable: 'on_request',
        feedbackTiming: 'after_attempt',
        scaffoldDensity: 'medium',
        allowSkip: true,
        focusVerbs: ['use', 'demonstrate', 'solve', 'implement']
      };
    
    case LearningGear.GEAR_3_ANALYZE:
      // Bloom's: Analyze - Break down and examine relationships
      return {
        cognitiveLevel: 'analyze',
        questionType: 'open_ended',
        hintsAvailable: 'limited',
        feedbackTiming: 'summative',
        scaffoldDensity: 'low',
        allowSkip: true,
        focusVerbs: ['analyze', 'compare', 'contrast', 'examine', 'distinguish']
      };
    
    case LearningGear.GEAR_4_EVALUATE_CREATE:
      // Bloom's: Evaluate/Create - Critical judgment and synthesis
      return {
        cognitiveLevel: 'evaluate_create',
        questionType: 'synthesis',
        hintsAvailable: 'none',
        feedbackTiming: 'summative',
        scaffoldDensity: 'minimal',
        allowSkip: true,
        focusVerbs: ['evaluate', 'justify', 'critique', 'design', 'create', 'synthesize']
      };
  }
}
```

### 2. Assumption Finder

**Responsibility**: Identifies hidden assumptions in user questions using pattern matching.

**Interface**:
```typescript
interface AssumptionFinder {
  findAssumptions(question: string): Assumption[];
  generatePrompt(assumption: Assumption): string;
  evaluateResponse(response: string, assumption: Assumption): AssumptionCheck;
}

type Assumption = {
  text: string;
  type: AssumptionType;
  confidence: number;
  prompt: string;
  hint: string;
};

enum AssumptionType {
  SOLUTION_ASSUMED,   // "How do I X?" assumes X is right approach
  CAUSALITY_ASSUMED,  // Assuming X causes Y without evidence
  SCOPE_ASSUMED,      // Missing boundary conditions
  DEFINITION_ASSUMED, // Undefined terms
  PERSPECTIVE_ASSUMED // Single viewpoint assumed
}
```

**Assumption Detection Patterns**:
```typescript
const ASSUMPTION_PATTERNS = {
  solution_assumed: {
    patterns: [
      /how (?:do|can) I (.+)\?/i,
      /what's the best way to (.+)\?/i,
      /should I (.+)\?/i
    ],
    promptTemplate: "You asked about {action}. What problem are you trying to solve? Are there other approaches?"
  },
  
  causality_assumed: {
    patterns: [
      /(.+) causes (.+)/i,
      /because of (.+), (.+) happens/i,
      /(.+) leads to (.+)/i
    ],
    promptTemplate: "You said {cause} causes {effect}. What evidence supports this? Could there be other factors?"
  },
  
  scope_assumed: {
    patterns: [
      /always (.+)/i,
      /never (.+)/i,
      /all (.+)/i,
      /every (.+)/i
    ],
    promptTemplate: "You said this applies to all cases. What are the boundary conditions? When might this not hold?"
  },
  
  definition_assumed: {
    patterns: [
      /what is (.+)\?/i,
      /explain (.+)/i
    ],
    promptTemplate: "What do you mean by {term}? How would you define it?"
  }
};

function findAssumptions(question: string): Assumption[] {
  const found: Assumption[] = [];
  
  for (const [type, config] of Object.entries(ASSUMPTION_PATTERNS)) {
    for (const pattern of config.patterns) {
      const match = question.match(pattern);
      if (match) {
        found.push({
          text: match[0],
          type: type as AssumptionType,
          confidence: 0.8,
          prompt: generatePromptFromTemplate(config.promptTemplate, match),
          hint: generateHint(type, match)
        });
      }
    }
  }
  
  return found;
}
```

### 3. Concept Library

**Responsibility**: Provides pre-built reasoning frameworks for common domains.

**Interface**:
```typescript
interface ConceptLibrary {
  getFramework(domain: string): ReasoningFramework | null;
  matchDomain(question: string): string | null;
  listAvailableFrameworks(): string[];
}

type ReasoningFramework = {
  id: string;
  name: string;
  domain: string;
  corePrinciple: string;
  prerequisites: string[];
  guidingQuestions: string[];
  examples: Example[];
  commonMisconceptions: string[];
};

type Example = {
  scenario: string;
  explanation: string;
  difficulty: 'beginner' | 'intermediate' | 'advanced';
};
```

**Example Frameworks**:
```typescript
const FRAMEWORKS: Record<string, ReasoningFramework> = {
  'algorithms': {
    id: 'algorithms',
    name: 'Algorithm Design',
    domain: 'computer_science',
    corePrinciple: "Algorithms solve problems through systematic step-by-step procedures",
    prerequisites: ['logic', 'data_structures'],
    guidingQuestions: [
      "What is the input and output?",
      "What are the constraints?",
      "What patterns does this problem match?",
      "What's the simplest approach that could work?",
      "How can we optimize?"
    ],
    examples: [
      {
        scenario: "Finding an item in a list",
        explanation: "Linear search checks each item. Binary search (if sorted) divides and conquers.",
        difficulty: 'beginner'
      },
      {
        scenario: "Shortest path in a graph",
        explanation: "BFS for unweighted, Dijkstra for weighted, A* with heuristics.",
        difficulty: 'intermediate'
      }
    ],
    commonMisconceptions: [
      "Faster always means better (ignores readability, maintainability)",
      "Optimization before correctness",
      "One algorithm fits all cases"
    ]
  },
  
  'physics': {
    id: 'physics',
    name: 'Physical Systems',
    domain: 'physics',
    corePrinciple: "Physical systems follow conservation laws and can be analyzed through forces, energy, and momentum",
    prerequisites: ['mathematics', 'units'],
    guidingQuestions: [
      "What are the forces acting on the system?",
      "What is conserved (energy, momentum, charge)?",
      "What are the initial and final states?",
      "What assumptions simplify the problem?",
      "How do we verify the answer makes physical sense?"
    ],
    examples: [
      {
        scenario: "Object falling under gravity",
        explanation: "Potential energy converts to kinetic energy. PE = mgh, KE = ½mv²",
        difficulty: 'beginner'
      },
      {
        scenario: "Collision between objects",
        explanation: "Momentum is conserved. Elastic collisions also conserve kinetic energy.",
        difficulty: 'intermediate'
      }
    ],
    commonMisconceptions: [
      "Heavier objects fall faster (ignoring air resistance)",
      "Force is needed for motion (confusing force with velocity)",
      "Energy can be created or destroyed"
    ]
  },
  
  'systems_thinking': {
    id: 'systems_thinking',
    name: 'Systems Analysis',
    domain: 'general',
    corePrinciple: "Complex systems have interconnected parts where changes propagate through feedback loops",
    prerequisites: ['causality', 'feedback'],
    guidingQuestions: [
      "What are the system components?",
      "How do components interact?",
      "What are the feedback loops?",
      "What are the delays in the system?",
      "What happens if we change one part?"
    ],
    examples: [
      {
        scenario: "Traffic congestion",
        explanation: "More cars → slower speeds → more time on road → more cars present → worse congestion (reinforcing loop)",
        difficulty: 'intermediate'
      },
      {
        scenario: "Thermostat control",
        explanation: "Temperature drops → heater turns on → temperature rises → heater turns off (balancing loop)",
        difficulty: 'beginner'
      }
    ],
    commonMisconceptions: [
      "Linear thinking in non-linear systems",
      "Ignoring delays and feedback",
      "Optimizing parts instead of the whole"
    ]
  }
};
```

### 4. Knowledge Tracker

**Responsibility**: Tracks concept understanding and schedules reviews using spaced repetition.

**Interface**:
```typescript
interface KnowledgeTracker {
  recordEngagement(conceptId: string, depth: number): void;
  getUnderstandingLevel(conceptId: string): number;
  getConceptsNeedingReview(): string[];
  updateAfterReflection(conceptId: string, accuracy: number): void;
  getConceptMap(): ConceptMap;
}

type ConceptRecord = {
  conceptId: string;
  understandingLevel: number;  // 0-100
  lastReview: number;          // timestamp
  reviewCount: number;
  connectedConcepts: string[];
  engagementHistory: Engagement[];
};

type Engagement = {
  timestamp: number;
  depth: number;  // 1-5, how deeply explored
  accuracy: number; // 0-1, if reflection occurred
};

type ConceptMap = {
  concepts: Record<string, ConceptRecord>;
  connections: Array<[string, string]>;
};
```

**Understanding Level Decay**:
```typescript
function calculateUnderstandingLevel(record: ConceptRecord): number {
  const daysSinceReview = (Date.now() - record.lastReview) / (1000 * 60 * 60 * 24);
  const decayRate = 0.1; // 10% per day
  
  // Exponential decay: level = initial × e^(-λ × days)
  const decayedLevel = record.understandingLevel * Math.exp(-decayRate * daysSinceReview);
  
  // Clamp to 0-100 range
  return Math.max(0, Math.min(100, decayedLevel));
}

function shouldReview(record: ConceptRecord): boolean {
  const currentLevel = calculateUnderstandingLevel(record);
  return currentLevel < 50;
}

function prioritizeReviews(records: ConceptRecord[]): ConceptRecord[] {
  return records
    .filter(shouldReview)
    .sort((a, b) => {
      const levelA = calculateUnderstandingLevel(a);
      const levelB = calculateUnderstandingLevel(b);
      // Lower level = higher priority
      return levelA - levelB;
    });
}
```

### 5. Reflection Validator

**Responsibility**: Evaluates user explanations to validate understanding.

**Interface**:
```typescript
interface ReflectionValidator {
  generatePrompt(conceptId: string, context: SessionContext): ReflectionPrompt;
  evaluateResponse(prompt: ReflectionPrompt, response: string): ReflectionResult;
  provideFeedback(result: ReflectionResult): string;
}

type ReflectionPrompt = {
  question: string;
  conceptId: string;
  expectedElements: string[];
  difficulty: 'easy' | 'medium' | 'hard';
};

type ReflectionResult = {
  accuracy: number;      // 0-1
  completeness: number;  // 0-1
  depth: number;         // 0-1
  missingElements: string[];
  feedback: string;
  gearRecommendation: 'advance' | 'maintain' | 'retreat';
};
```

**Reflection Prompt Generation**:
```typescript
function generateReflectionPrompt(
  conceptId: string,
  context: SessionContext
): ReflectionPrompt {
  const concept = context.knowledgeMap.concepts[conceptId];
  const framework = getFramework(concept.domain);
  
  const promptTemplates = [
    {
      template: "Explain how {concept} works in your own words.",
      expectedElements: ["core mechanism", "key components", "example"],
      difficulty: 'easy'
    },
    {
      template: "How does {concept} relate to {related}?",
      expectedElements: ["relationship type", "shared principles", "differences"],
      difficulty: 'medium'
    },
    {
      template: "What would happen if {assumption} wasn't true for {concept}?",
      expectedElements: ["identifies assumption", "explores implications", "derives consequences"],
      difficulty: 'hard'
    },
    {
      template: "Teach {concept} to someone who's never heard of it.",
      expectedElements: ["simple analogy", "builds from basics", "checks understanding"],
      difficulty: 'medium'
    }
  ];
  
  // Select template based on current gear
  const gearDifficulty = getGearDifficulty(context.currentGear);
  const suitable = promptTemplates.filter(t => t.difficulty === gearDifficulty);
  const selected = randomChoice(suitable);
  
  return {
    question: selected.template
      .replace('{concept}', concept.name)
      .replace('{related}', getRelatedConcept(concept))
      .replace('{assumption}', getAssumption(concept)),
    conceptId,
    expectedElements: selected.expectedElements,
    difficulty: selected.difficulty
  };
}
```

**Response Evaluation**:
```typescript
function evaluateResponse(
  prompt: ReflectionPrompt,
  response: string
): ReflectionResult {
  // Check for expected elements
  const foundElements = prompt.expectedElements.filter(element =>
    responseContainsElement(response, element)
  );
  
  const completeness = foundElements.length / prompt.expectedElements.length;
  
  // Evaluate accuracy (simplified - would use LLM in practice)
  const accuracy = evaluateAccuracy(response, prompt.conceptId);
  
  // Evaluate depth (checks for foundational principles)
  const depth = evaluateDepth(response, prompt.conceptId);
  
  // Determine gear recommendation
  const overallScore = (accuracy * 0.5) + (completeness * 0.3) + (depth * 0.2);
  let gearRecommendation: 'advance' | 'maintain' | 'retreat';
  
  if (overallScore > 0.8) {
    gearRecommendation = 'advance';
  } else if (overallScore < 0.5) {
    gearRecommendation = 'retreat';
  } else {
    gearRecommendation = 'maintain';
  }
  
  return {
    accuracy,
    completeness,
    depth,
    missingElements: prompt.expectedElements.filter(e => !foundElements.includes(e)),
    feedback: generateFeedback(accuracy, completeness, depth, prompt.expectedElements),
    gearRecommendation
  };
}

function generateFeedback(
  accuracy: number,
  completeness: number,
  depth: number,
  expectedElements: string[]
): string {
  const feedback: string[] = [];
  
  // Effort-based feedback (not ability-based)
  if (accuracy > 0.7) {
    feedback.push("You worked through that systematically.");
  }
  
  // Specific gap identification
  if (completeness < 0.7) {
    feedback.push(`Consider exploring: ${expectedElements.join(', ')}`);
  }
  
  // Strategy suggestions
  if (depth < 0.5) {
    feedback.push("Try tracing this back to more foundational principles.");
  }
  
  return feedback.join(' ');
}
```

### 6. Response Builder

**Responsibility**: Constructs user-facing responses based on gear and scaffolding plan.

**Interface**:
```typescript
interface ResponseBuilder {
  buildResponse(
    plan: ScaffoldingPlan,
    gear: LearningGear,
    context: SessionContext
  ): Response;
  
  formatForGear(content: ResponseContent, gear: LearningGear): FormattedResponse;
}

type ResponseContent = {
  guidingQuestions: string[];
  hints: string[];
  examples: string[];
  reflectionPrompt?: string;
};

type FormattedResponse = {
  text: string;
  interactiveElements: InteractiveElement[];
  formatting: FormattingCue[];
};

type InteractiveElement = 
  | { type: 'multiple_choice', options: string[], correctIndex: number }
  | { type: 'hint_reveal', hints: string[], maxReveals: number }
  | { type: 'progress_indicator', current: number, total: number }
  | { type: 'reflection_input', prompt: string };
```

**Gear-Specific Formatting (Bloom's Taxonomy)**:
```typescript
function formatForGear(
  content: ResponseContent,
  gear: LearningGear
): FormattedResponse {
  switch (gear) {
    case LearningGear.GEAR_1_UNDERSTAND:
      // Bloom's: Understand - Comprehension with scaffolding
      return {
        text: formatAsMultipleChoice(content),
        interactiveElements: [
          {
            type: 'multiple_choice',
            options: generateChoices(content.guidingQuestions[0]),
            correctIndex: 0
          },
          {
            type: 'hint_reveal',
            hints: content.hints,
            maxReveals: 999 // Unlimited in Gear 1
          }
        ],
        formatting: [
          { type: 'bold', text: 'Key Concept' },
          { type: 'progress', current: 1, total: 4 },
          { type: 'cognitive_level', level: 'Understanding: Grasp concepts and relationships' }
        ]
      };
    
    case LearningGear.GEAR_2_APPLY:
      // Bloom's: Apply - Use concepts in new situations
      return {
        text: formatAsApplication(content),
        interactiveElements: [
          {
            type: 'hint_reveal',
            hints: content.hints,
            maxReveals: 3
          },
          {
            type: 'scenario_input',
            prompt: 'How would you apply this concept to solve...'
          }
        ],
        formatting: [
          { type: 'blockquote', text: content.guidingQuestions[0] },
          { type: 'expandable', label: 'Need a hint?', content: content.hints[0] },
          { type: 'cognitive_level', level: 'Applying: Use concepts in new situations' }
        ]
      };
    
    case LearningGear.GEAR_3_ANALYZE:
      // Bloom's: Analyze - Break down and examine relationships
      return {
        text: formatAsAnalysis(content),
        interactiveElements: [
          {
            type: 'comparison_matrix',
            prompt: 'Compare and contrast these approaches'
          }
        ],
        formatting: [
          { type: 'blockquote', text: content.guidingQuestions[0] },
          { type: 'emphasis', text: 'Break this down into components' },
          { type: 'cognitive_level', level: 'Analyzing: Examine relationships and patterns' }
        ]
      };
    
    case LearningGear.GEAR_4_EVALUATE_CREATE:
      // Bloom's: Evaluate/Create - Critical judgment and synthesis
      return {
        text: formatAsSynthesis(content),
        interactiveElements: [
          {
            type: 'evaluation_input',
            prompt: 'Evaluate this approach and propose improvements'
          },
          {
            type: 'creation_canvas',
            prompt: 'Design a solution that addresses...'
          }
        ],
        formatting: [
          { type: 'challenge_badge', level: 'Evaluate & Create' },
          { type: 'cognitive_level', level: 'Evaluating/Creating: Critical judgment and synthesis' }
        ]
      };
  }
}
```

### 7. Whiteboard Controller (Khan Academy Style)

**Responsibility**: Manages the digital whiteboard for visual explanations synchronized with audio.

**Interface**:
```typescript
interface WhiteboardController {
  drawConcept(concept: string, style: VisualizationStyle): WhiteboardElement[];
  animateReasoningStep(step: ReasoningStep, duration: number): Animation;
  highlightElement(elementId: string): void;
  synchronizeWithAudio(audioTimestamp: number): void;
  enableUserInteraction(mode: InteractionMode): void;
  replayExplanation(startTime: number, endTime: number): void;
}

type WhiteboardElement = 
  | { type: 'diagram', data: DiagramData, position: Point }
  | { type: 'text', content: string, position: Point, style: TextStyle }
  | { type: 'arrow', from: Point, to: Point, label?: string }
  | { type: 'shape', shape: 'circle' | 'rectangle' | 'line', bounds: Bounds }
  | { type: 'annotation', text: string, target: string };

type VisualizationStyle = {
  complexity: 'simple' | 'moderate' | 'detailed';  // Based on gear
  colorScheme: 'default' | 'high_contrast';
  animationSpeed: 'slow' | 'normal' | 'fast';
  showLabels: boolean;
  showSteps: boolean;
};

type Animation = {
  elements: WhiteboardElement[];
  timeline: AnimationKeyframe[];
  duration: number;
  syncPoints: number[];  // Audio sync timestamps
};

type InteractionMode = 
  | 'view_only'           // Gear 1: Watch and learn
  | 'annotate'            // Gear 2: Add notes and highlights
  | 'fill_gaps'           // Gear 3: Complete partial diagrams
  | 'create'              // Gear 4: Draw and explain
```

**Visual Explanation Generation**:
```typescript
function generateVisualExplanation(
  concept: string,
  gear: LearningGear,
  framework: ReasoningFramework
): WhiteboardSequence {
  const style = getVisualizationStyle(gear);
  
  switch (gear) {
    case LearningGear.GEAR_1_UNDERSTAND:
      // Detailed diagrams with full labels
      return {
        elements: [
          drawConceptDiagram(concept, 'detailed'),
          addLabels(concept, 'all'),
          addAnnotations(concept, framework.guidingQuestions)
        ],
        interactionMode: 'view_only',
        audioScript: generateExplanationScript(concept, 'detailed')
      };
    
    case LearningGear.GEAR_2_APPLY:
      // Structured visuals with gaps for user to fill
      return {
        elements: [
          drawConceptDiagram(concept, 'moderate'),
          addLabels(concept, 'partial'),
          createFillableGaps(concept)
        ],
        interactionMode: 'fill_gaps',
        audioScript: generateExplanationScript(concept, 'guided')
      };
    
    case LearningGear.GEAR_3_ANALYZE:
      // Minimal scaffolding, user constructs
      return {
        elements: [
          drawConceptOutline(concept),
          provideDrawingTools()
        ],
        interactionMode: 'create',
        audioScript: generateExplanationScript(concept, 'minimal')
      };
    
    case LearningGear.GEAR_4_EVALUATE_CREATE:
      // Blank canvas, user creates and explains
      return {
        elements: [
          provideBlankCanvas(),
          provideAdvancedTools()
        ],
        interactionMode: 'create',
        audioScript: "Show me how you would visualize this concept."
      };
  }
}
```

**Audio-Visual Synchronization**:
```typescript
function synchronizeAudioVisual(
  audioScript: string,
  visualElements: WhiteboardElement[]
): SynchronizedPresentation {
  // Parse audio script into segments
  const audioSegments = parseAudioScript(audioScript);
  
  // Create sync points
  const syncPoints: SyncPoint[] = [];
  let currentTime = 0;
  
  for (let i = 0; i < audioSegments.length; i++) {
    const segment = audioSegments[i];
    const element = visualElements[i];
    
    syncPoints.push({
      audioTime: currentTime,
      visualAction: {
        type: 'draw',
        element: element,
        duration: segment.estimatedDuration * 0.8  // Draw slightly before speaking
      }
    });
    
    currentTime += segment.estimatedDuration;
  }
  
  return {
    audioTrack: audioSegments,
    visualTimeline: syncPoints,
    totalDuration: currentTime
  };
}
```

**Natural Teaching Patterns**:
```typescript
const TEACHING_PATTERNS = {
  progressive_disclosure: {
    // Reveal complexity gradually
    steps: [
      'Show simple overview',
      'Add first layer of detail',
      'Add second layer of detail',
      'Show complete picture'
    ]
  },
  
  visual_metaphor: {
    // Use familiar visuals for abstract concepts
    examples: {
      'algorithm': 'recipe with steps',
      'recursion': 'nested boxes',
      'tree_structure': 'family tree',
      'graph': 'city map with roads'
    }
  },
  
  step_by_step_reveal: {
    // Draw as you explain
    pattern: 'audio_then_visual',  // Speak, then draw
    timing: {
      audioLead: 200,  // Start audio 200ms before drawing
      drawDuration: 1000  // Draw over 1 second
    }
  },
  
  highlight_on_mention: {
    // Highlight elements when mentioned in audio
    pattern: 'sync_highlight',
    duration: 500  // Highlight for 500ms
  }
};

function applyTeachingPattern(
  pattern: TeachingPattern,
  content: ResponseContent
): WhiteboardSequence {
  switch (pattern) {
    case 'progressive_disclosure':
      return createProgressiveSequence(content);
    
    case 'visual_metaphor':
      return createMetaphorVisualization(content);
    
    case 'step_by_step_reveal':
      return createStepByStepSequence(content);
    
    case 'highlight_on_mention':
      return createHighlightSequence(content);
  }
}
```

**User Interaction Handling**:
```typescript
function handleUserDrawing(
  userInput: DrawingInput,
  context: SessionContext
): DrawingFeedback {
  // Evaluate user's visual explanation
  const evaluation = evaluateVisualExplanation(userInput, context.currentConcept);
  
  return {
    accuracy: evaluation.accuracy,
    completeness: evaluation.completeness,
    suggestions: [
      evaluation.missingElements.map(e => `Consider adding: ${e}`),
      evaluation.incorrectElements.map(e => `Review: ${e}`)
    ],
    visualFeedback: {
      highlightCorrect: evaluation.correctElements,
      highlightIncorrect: evaluation.incorrectElements,
      showMissing: evaluation.missingElements
    }
  };
}
```

## Data Models

### Session Context
```typescript
type SessionContext = {
  sessionId: string;
  userId: string;
  currentGear: LearningGear;
  startTime: number;
  conversationHistory: Message[];
  knowledgeMap: ConceptMap;
  recentReflections: ReflectionResult[];
  recentResponseTimes: number[];
  scaffoldingTrace: ScaffoldingTrace;
  userRequestedGear?: LearningGear;
};

type Message = {
  timestamp: number;
  role: 'user' | 'assistant';
  content: string;
  conceptsActivated: string[];
  scaffoldingUsed: string[];
};

type ScaffoldingTrace = {
  used: string[];
  bypassed: string[];
  reflectionsTriggered: number;
  assumptionsIdentified: number;
};
```

### Knowledge Map
```typescript
type ConceptMap = {
  concepts: Record<string, ConceptRecord>;
  connections: Array<[string, string]>;
  lastUpdated: number;
};

type ConceptRecord = {
  conceptId: string;
  name: string;
  domain: string;
  understandingLevel: number;  // 0-100
  lastReview: number;
  reviewCount: number;
  connectedConcepts: string[];
  engagementHistory: Engagement[];
};
```

### Learning Metrics
```typescript
type LearningMetrics = {
  conceptsExplored: number;
  reasoningDepth: number;
  independenceLevel: number;  // % without scaffolding
  averageUnderstanding: number;
  reflectionAccuracy: number;
  currentStreak: number;
  milestones: Milestone[];
};

type Milestone = {
  type: 'first_independent' | 'deepest_reasoning' | 'perfect_reflection' | 'gear_advance';
  timestamp: number;
  details: string;
};
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*


### Property 1: Structured Reasoning Path
*For any* user question, the system SHALL present a structured reasoning path containing the four steps (Identify Assumptions → Find Dependencies → Connect to Principles → Apply Understanding) rather than a direct answer.
**Validates: Requirements 1.1, 1.2**

### Property 2: Step Advancement on Understanding
*For any* reasoning step and any demonstration of understanding, the system SHALL advance to the next step in the sequence.
**Validates: Requirements 1.3**

### Property 3: Graduated Hints on Struggle
*For any* struggle signal at a reasoning step, the system SHALL provide hints that guide without revealing the direct answer.
**Validates: Requirements 1.4**

### Property 4: Scaffolding Trace Completeness
*For any* interaction, the internal trace SHALL record all scaffolding supports used and bypassed, including assumptions identified, frameworks activated, and reflection results.
**Validates: Requirements 1.5, 6.2**

### Property 5: Gear Selection Factors
*For any* gear selection decision, the system SHALL consider recent accuracy, response time fluency, and explicit user requests, with accuracy weighted at 70% and fluency at 30%.
**Validates: Requirements 2.2**

### Property 6: Gear Transition Thresholds
*For any* sequence of interactions, the system SHALL suggest gear advancement when accuracy > 80% and offer gear retreat when accuracy < 50%, with user consent required before any change.
**Validates: Requirements 2.3, 2.4, 2.5**

### Property 7: Gear Change Visibility
*For any* gear change, the system SHALL include visible indication in the user response.
**Validates: Requirements 2.6**

### Property 8: Knowledge Tracker Structure
*For any* concept in the knowledge tracker, the record SHALL contain: understanding level (0-100), last review timestamp, review count, and connected concepts list.
**Validates: Requirements 3.1**

### Property 9: Understanding Level Decay
*For any* concept, the understanding level SHALL decay according to the formula: `level = initial_level × e^(-0.1 × days_since_review)`, clamped to the range [0, 100].
**Validates: Requirements 3.2**

### Property 10: Review Prompting Threshold
*For any* concept with understanding level below 50, the system SHALL include it in the review prompt list, prioritized by lowest level first.
**Validates: Requirements 3.3, 3.4**

### Property 11: Concept Map Visualization
*For any* knowledge tracker state, the visualization SHALL include all concepts and their connections.
**Validates: Requirements 3.5**

### Property 12: Reflection Timing
*For any* concept, after 3-5 exchanges focused on that concept, the system SHALL trigger a reflection check.
**Validates: Requirements 4.1**

### Property 13: Reflection Question Format
*For any* reflection check, the question SHALL match one of the specified patterns: explain relationship, explore counterfactual, or teach to beginner.
**Validates: Requirements 4.2**

### Property 14: Reflection Evaluation Dimensions
*For any* user explanation in a reflection, the system SHALL evaluate all three dimensions: accuracy (correct relationships), completeness (no missing connections), and depth (reaches foundational principles).
**Validates: Requirements 4.3**

### Property 15: Gap-Identifying Feedback
*For any* reflection feedback, it SHALL identify specific gaps without providing direct answers to those gaps.
**Validates: Requirements 4.4**

### Property 16: Reflection State Updates
*For any* reflection result, successful reflections (score > 0.7) SHALL increase understanding level, while struggling reflections (score < 0.5) SHALL trigger additional scaffolding.
**Validates: Requirements 4.5, 4.6**

### Property 17: Assumption Analysis
*For any* user question, the system SHALL analyze for implicit assumptions using pattern matching before generating the reasoning path.
**Validates: Requirements 5.1**

### Property 18: Assumption Presentation Format
*For any* identified assumption, it SHALL be presented as a question that challenges the assumption.
**Validates: Requirements 5.2**

### Property 19: Assumption Engagement Flow
*For any* identified assumption, the system SHALL wait for user engagement with the assumption question before proceeding, providing graduated hints if the user struggles.
**Validates: Requirements 5.3, 5.4, 5.5**

### Property 20: Dual Output Generation
*For any* interaction, the system SHALL generate both an internal trace (JSON with reasoning steps, scaffolding used, confidence scores) and a user response (natural language formatted for current gear).
**Validates: Requirements 6.1**

### Property 21: Gear-Matched Response Format
*For any* user response, the format SHALL match the current learning gear: multiple choice for Gear 1, guided with hints for Gear 2, open-ended for Gear 3, challenge for Gear 4.
**Validates: Requirements 6.3**

### Property 22: Learning-Preserving Information Filtering
*For any* user-facing response, details that could undermine learning (such as confidence scores or correct answer indicators) SHALL be filtered from display.
**Validates: Requirements 6.5**

### Property 23: Response Formatting
*For any* text response, appropriate formatting SHALL be applied: bold for key concepts, blockquotes for questions, code blocks for technical examples, numbered lists for sequential steps.
**Validates: Requirements 7.2**

### Property 24: Gear-Appropriate Interactive Elements
*For any* response in Gear 1 or Gear 2, interactive elements (multiple choice, hint reveals, progress indicators) SHALL be included.
**Validates: Requirements 7.3**

### Property 25: Struggle Signal Detection
*For any* interaction showing struggle patterns (short responses, repeated questions, explicit confusion), the system SHALL detect and record the struggle signal.
**Validates: Requirements 7.4**

### Property 26: Engagement-Required Answers
*For any* question, direct answers SHALL only be provided after user engagement with scaffolding (assumptions, reasoning steps, or reflections).
**Validates: Requirements 8.1**

### Property 27: Comprehensive Feedback Quality
*For any* feedback, it SHALL be effort-based (not ability-based), identify specific gaps, and suggest concrete strategies.
**Validates: Requirements 8.2, 8.3, 8.4**

### Property 28: Shortcut Request Handling
*For any* direct answer request, the system SHALL explain the learning cost and offer scaffolding as an alternative.
**Validates: Requirements 8.5**

### Property 29: Metrics Tracking Completeness
*For any* session, the system SHALL track all specified metrics: concepts explored, reasoning depth, independence level, average understanding, and reflection accuracy.
**Validates: Requirements 9.1**

### Property 30: Decline Response
*For any* metric showing decline over time, the system SHALL suggest a review session.
**Validates: Requirements 9.4**

### Property 31: Milestone Celebration
*For any* milestone reached (first independent reasoning, deepest exploration, perfect reflection, gear advance), the system SHALL record and celebrate it.
**Validates: Requirements 9.5**

### Property 32: Framework Structure Completeness
*For any* reasoning framework in the concept library, it SHALL include: core principle, prerequisites, guiding questions, examples, and common misconceptions.
**Validates: Requirements 10.2**

### Property 33: Domain-Based Framework Activation
*For any* user question matching a domain in the concept library, the system SHALL activate the corresponding framework.
**Validates: Requirements 10.3**

### Property 34: Reasoning Coherence Validation (Phase 2)
*For any* generated reasoning path, the system SHALL validate for logical consistency, progress toward fundamentals, and absence of circular reasoning.
**Validates: Requirements 11.1, 11.2**

### Property 35: Coherence-Based Actions (Phase 2)
*For any* coherence score, the system SHALL take the appropriate action: proceed if high (>0.8), add scaffolding if medium (0.5-0.8), flag divergence if low (<0.5).
**Validates: Requirements 11.3**

### Property 36: Cycle Detection (Phase 2)
*For any* reasoning path longer than 5 steps, the system SHALL detect circular reasoning patterns and suggest alternative perspectives.
**Validates: Requirements 11.5**

### Property 37: API Reliability (Phase 2)
*For any* validation API call, the system SHALL complete within 500ms or fall back to cached/approximate results, with structured responses containing validation status, coherence score, and suggestions.
**Validates: Requirements 12.3, 12.4, 12.5**

## Error Handling

### Scaffolding Errors

**Missing Framework**:
- Detection: User question matches domain with no framework defined
- Handling: Use generic reasoning scaffold with basic guiding questions
- Recovery: Log missing framework for future development

**Gear Selection Oscillation**:
- Detection: Gear changes more than once per 3 exchanges
- Handling: Apply hysteresis (10% margin required for change)
- Recovery: Maintain current gear, log pattern

### Knowledge Tracker Errors

**Understanding Level Out of Range**:
- Detection: Calculated level outside [0, 100]
- Handling: Clamp to valid range, log anomaly
- Recovery: Continue with clamped value

**Decay Calculation Error**:
- Detection: Exponential calculation produces NaN or Infinity
- Handling: Reset to default level (50)
- Recovery: Log error, continue with reset value

### Reflection Errors

**Evaluation Failure**:
- Detection: Cannot evaluate user response
- Handling: Provide generic encouraging feedback
- Recovery: Don't update understanding level, log for review

**Prompt Generation Failure**:
- Detection: Cannot generate reflection prompt
- Handling: Skip reflection for this cycle
- Recovery: Trigger reflection on next opportunity

### Graceful Degradation

**When Knowledge Tracker Fails**:
- System continues with basic interaction (no tracking)
- Disable spaced repetition features
- Notify user of limited tracking

**When Concept Library Unavailable**:
- Use generic reasoning scaffold for all questions
- Log error, continue with basic scaffolding

**When Reflection Validator Fails**:
- Skip reflection checks
- Continue with gear-based scaffolding
- Log error for investigation

## Testing Strategy

### Unit Tests

**Scaffolding Controller**:
- Test gear selection with various accuracy/fluency combinations
- Test hysteresis prevents rapid oscillation
- Test user override handling
- Test reflection timing (3-5 exchanges)

**Knowledge Tracker**:
- Test decay calculation for various time intervals
- Test understanding level clamping
- Test review priority ordering
- Test concept connection tracking

**Assumption Finder**:
- Test pattern matching for each assumption type
- Test prompt generation from templates
- Test assumption evaluation

**Response Builder**:
- Test gear-appropriate formatting
- Test interactive element generation
- Test information filtering (no confidence scores)

### Property-Based Tests

Each property test should run minimum 100 iterations and be tagged with the property it validates.

**Property 9: Understanding Level Decay**
```typescript
// Tag: Feature: principia-mvp, Property 9: Understanding level decay formula
test('understanding level decays exponentially', () => {
  fc.assert(
    fc.property(
      fc.integer({ min: 0, max: 100 }),  // initial level
      fc.integer({ min: 0, max: 365 }),  // days since review
      (initialLevel, days) => {
        const result = calculateUnderstandingLevel({
          understandingLevel: initialLevel,
          lastReview: Date.now() - (days * 24 * 60 * 60 * 1000),
          // ... other fields
        });
        
        // Should be in valid range
        expect(result).toBeGreaterThanOrEqual(0);
        expect(result).toBeLessThanOrEqual(100);
        
        // Should decay (or stay same if already 0)
        expect(result).toBeLessThanOrEqual(initialLevel);
        
        // Should follow exponential formula
        const expected = initialLevel * Math.exp(-0.1 * days);
        expect(result).toBeCloseTo(Math.max(0, Math.min(100, expected)), 1);
      }
    ),
    { numRuns: 100 }
  );
});
```

**Property 6: Gear Transition Thresholds**
```typescript
// Tag: Feature: principia-mvp, Property 6: Gear transition thresholds
test('gear transitions follow accuracy thresholds', () => {
  fc.assert(
    fc.property(
      fc.array(fc.float({ min: 0, max: 1 }), { minLength: 5, maxLength: 10 }),
      fc.integer({ min: 1, max: 4 }),
      (accuracyHistory, currentGear) => {
        const avgAccuracy = accuracyHistory.reduce((a, b) => a + b) / accuracyHistory.length;
        const context = createContext(accuracyHistory, currentGear);
        const recommendation = getGearRecommendation(context);
        
        if (avgAccuracy > 0.8 && currentGear < 4) {
          expect(recommendation).toBe('advance');
        } else if (avgAccuracy < 0.5 && currentGear > 1) {
          expect(recommendation).toBe('retreat');
        } else {
          expect(recommendation).toBe('maintain');
        }
      }
    ),
    { numRuns: 100 }
  );
});
```

**Property 1: Structured Reasoning Path**
```typescript
// Tag: Feature: principia-mvp, Property 1: Structured reasoning path
test('all responses contain four-step reasoning structure', () => {
  fc.assert(
    fc.property(
      fc.string({ minLength: 10, maxLength: 200 }),  // random question
      fc.integer({ min: 1, max: 4 }),                // random gear
      (question, gear) => {
        const response = processQuestion(question, { currentGear: gear });
        const internalTrace = response.internalTrace;
        
        // Should have reasoning steps
        expect(internalTrace.reasoningSteps).toBeDefined();
        
        // Should have all four steps
        const stepTypes = internalTrace.reasoningSteps.map(s => s.type);
        expect(stepTypes).toContain('identify_assumptions');
        expect(stepTypes).toContain('find_dependencies');
        expect(stepTypes).toContain('connect_principles');
        expect(stepTypes).toContain('apply_understanding');
        
        // Should not contain direct answer
        expect(response.userResponse).not.toMatch(/the answer is/i);
        expect(response.userResponse).not.toMatch(/the solution is/i);
      }
    ),
    { numRuns: 100 }
  );
});
```

**Property 19: Assumption Engagement Flow**
```typescript
// Tag: Feature: principia-mvp, Property 19: Assumption engagement flow
test('system waits for assumption engagement', () => {
  fc.assert(
    fc.property(
      fc.string({ minLength: 10, maxLength: 200 }),
      (question) => {
        const response = processQuestion(question, {});
        const assumptions = findAssumptions(question);
        
        if (assumptions.length > 0) {
          // Should present assumption as question
          expect(response.userResponse).toMatch(/\?/);
          
          // Should not proceed to answer
          expect(response.internalTrace.state).toBe('awaiting_assumption_engagement');
          
          // Should provide hints if requested
          const hintResponse = requestHint(response.sessionId);
          expect(hintResponse.hints.length).toBeGreaterThan(0);
          expect(hintResponse.hints[0]).not.toContain(assumptions[0].text);
        }
      }
    ),
    { numRuns: 100 }
  );
});
```

**Property 27: Comprehensive Feedback Quality**
```typescript
// Tag: Feature: principia-mvp, Property 27: Comprehensive feedback quality
test('feedback is effort-based and specific', () => {
  fc.assert(
    fc.property(
      fc.string({ minLength: 20, maxLength: 500 }),  // user explanation
      fc.float({ min: 0, max: 1 }),                  // accuracy
      (explanation, accuracy) => {
        const feedback = generateFeedback(explanation, accuracy);
        
        // Should not contain ability-based language
        expect(feedback).not.toMatch(/smart|intelligent|talented|gifted/i);
        
        // Should contain effort-based language
        expect(feedback).toMatch(/worked|thought|explored|considered/i);
        
        // Should identify specific gaps if accuracy < 0.7
        if (accuracy < 0.7) {
          expect(feedback).toMatch(/consider|explore|think about/i);
          expect(feedback.length).toBeGreaterThan(20);  // Specific, not generic
        }
        
        // Should suggest strategies if accuracy < 0.5
        if (accuracy < 0.5) {
          expect(feedback).toMatch(/try|approach|start with/i);
        }
      }
    ),
    { numRuns: 100 }
  );
});
```

### Integration Tests

**Complete Learning Flow**:
- User asks question → Assumptions identified → User engages → Reasoning path presented → User completes steps → Reflection triggered → Understanding updated

**Gear Progression**:
- User starts at Gear 1 → Succeeds consistently → Gear advances to 2 → Continues succeeding → Advances to 3 → Advances to 4

**Spaced Repetition**:
- Concept engaged → Time passes → Understanding decays → Review prompted → Review completed → Understanding restored

**Knowledge Map Building**:
- Multiple concepts explored → Connections identified → Map visualized → Related concepts linked

## Implementation Notes

### Technology Stack

**Core System**: TypeScript/Node.js
- Strong typing for complex state management
- Simple in-memory state for MVP (Redis for production)
- JSON for data persistence

**Testing**:
- Jest for unit tests
- fast-check for property-based tests
- Minimum 100 iterations per property test

**UI** (Phase 1):
- Interactive learning interface (NOT a standard chatbot)
- Structured reasoning workspace with:
  - Step-by-step reasoning progress tracker
  - Interactive assumption challenges (must engage before proceeding)
  - Expandable hint system (graduated reveals)
  - Multiple choice scaffolding (Gear 1)
  - Reflection input areas with evaluation
  - Visual knowledge map showing concept connections
  - Progress dashboard with metrics
- Markdown rendering for formatting
- Prevents "just ask and get answer" pattern through enforced engagement

### Performance Targets

- **Response Time**: < 500ms for scaffolding selection
- **Knowledge Tracker Updates**: < 50ms
- **Reflection Evaluation**: < 200ms
- **Assumption Analysis**: < 100ms

### Extensibility Points

**Custom Frameworks**:
- Add new frameworks to FRAMEWORKS registry
- Define domain, principles, and guiding questions
- Register common misconceptions

**Learning Analytics**:
- Export metrics to external systems
- Custom metric calculations
- Integration with learning platforms

**Phase 2 Additions**:
- Voice mode with predictive priming
- Advanced reasoning validation via API
- Multi-modal explanations (visual, analogical)

---

## Phase 2 Enhancements (Future)

### Reinforcement Learning Layer

**Objective**: Transform Principia into a self-improving teaching system that continuously adapts based on student interactions.

**Two-Level RL Framework**:
- **Agent Level**: AI tutor's policy learns optimal teaching strategies
- **Student Level**: Dynamic learner model adapts to individual patterns

**State Representation** (30-50 features):
- Concept knowledge levels (0-100 for each active concept)
- Recent performance (accuracy, response time, consecutive correct/incorrect)
- Current gear (one-hot encoding)
- Engagement metrics (interaction depth, questions asked)
- Struggle signals (none/mild/severe)
- Assumption handling (engagement, correctness)
- Time dynamics (days since review, session duration)
- Reflection history (scores, count)

**Action Space** (50-60 pedagogical actions):
- Gear selection (1-4)
- Scaffolding (provide hint level 1-3, ask guiding question, show example)
- Assumption challenges
- Reflection triggers
- Feedback (correctness, gap-specific)
- Difficulty adjustment (increase/decrease within gear)
- Engagement (ask for elaboration, suggest related concept)
- Wait (allow thinking time)

**Reward Function**:
- Immediate: Correctness (scaled by gear), engagement (+0.3), explanation (+0.5), assumption identification (+0.4)
- Delayed: Reflection scores, learning gains (ΔU), retention bonus, gear advancement
- Long-term: Retention decay penalty, transfer ability, session return rate

**Learning Algorithm**: PPO (Proximal Policy Optimization)
- Policy network: MLP with 2-3 hidden layers (256-512 units)
- Discount factor (γ): 0.99
- Training: Offline pre-training with simulated students → Online fine-tuning with real users

**Integration with Existing Components**:
- Knowledge Tracker → provides state features
- Scaffolding Controller → receives gear decisions from RL agent
- Reflection Validator → agent decides when to trigger reflections
- Safety Layer → rule-based overrides prevent harmful actions

**Implementation Phases**:
1. **Phase 2A**: Offline pre-training with simulated students (4 personas)
2. **Phase 2B**: Beta deployment (10-20% users) with safety overrides
3. **Phase 2C**: Full integration with online learning (nightly retraining)
4. **Phase 3**: Continuous improvement and multi-objective optimization

### Voice Mode
- 856ms latency optimization for predictive priming
- Prosodic cues for conceptual transitions
- Voice-specific struggle signal detection

### Advanced Reasoning Validation
- API-based coherence checking
- Cycle detection in reasoning paths
- Cross-session reasoning continuity

### Multi-Modal Explanations
- Visual diagrams and concept maps
- Analogical reasoning from familiar domains
- Interactive exploration modes

### Social Learning
- Peer explanation opportunities
- Collaborative reasoning sessions
- Learning community features
