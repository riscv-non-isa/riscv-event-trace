# RISC-V Event Trace Specification Analysis Report
**Date:** August 20, 2026

## Executive Summary

This report analyzes the RISC-V Event Trace specification structure across seven key AsciiDoc files for **structure consistency**, **readability**, **ease of understanding**, and **areas for improvement**. The specification is comprehensive but exhibits inconsistencies in formatting, terminology, reference handling, and organizational flow that impact reader comprehension and navigation.

---

## 1. OVERALL DOCUMENT STRUCTURE

### Current Organization
```
1. intro.adoc            - Introduction
2. body.adoc             - Features & Event Types (comprehensive)
3. RHTI-Extensions.adoc  - Hardware Interface Extensions
4. Trace-Control-Extensions.adoc - Control Register Specifications
5. N-Trace-packets.adoc  - N-Trace Format Message Definitions
6. E-Trace-packets.adoc  - E-Trace Format Packet Definitions
7. appendix.adoc         - Operations & Filtering (non-normative)
```

### Structural Strengths
✅ **Logical progression**: Introduction → Features → Implementation Details → Message Formats  
✅ **Clear separation of concerns**: Different protocols (N-Trace vs E-Trace) handled separately  
✅ **Comprehensive coverage**: All major aspects addressed  

### Structural Weaknesses
❌ **Fragmented presentation**: Event types defined in `body.adoc` but detailed message definitions scattered across `N-Trace-packets.adoc` and `E-Trace-packets.adoc`  
❌ **Missing glossary**: No centralized definitions of terminology (e.g., F-ADDR vs U-ADDR, EVTSRC, TCODE)  
❌ **Unclear navigation**: Cross-references between files are inconsistent; readers must jump between documents to understand complete event lifecycle  
❌ **Inconsistent file naming**: File names lack clear naming convention (mixing kebab-case and PascalCase with hyphens)

**Recommendation**: Create a dedicated "Terminology & Glossary" section at the beginning and add a "Quick Reference" mapping Event Types → Message Formats → Register Controls.

---

## 2. CONSISTENCY ANALYSIS

### 2.1 Terminology Inconsistencies

| Term | Usage Locations | Inconsistency Issue |
|------|-----------------|---------------------|
| **Message vs. Packet** | N-Trace uses "message"; E-Trace uses "packet"; both used interchangeably | Confusing for readers; N-Trace-packets.adoc explicitly states "message" is N-Trace term, but section is still titled with "packets" |
| **Event Types (capitalization)** | Inconsistent: "Event Types", "event types", "Event type" | Minor but affects precision |
| **Timestamp types** | "Timestamp", "TSTAMP", "TS", "trTsControl" | Multiple abbreviations for same concept; inconsistent naming in registers vs. descriptions |
| **Address encoding** | "F-ADDR", "F-address", "full address" | Mixed terminology in tables vs. prose descriptions |
| **Compression terms** | "Compressed", "U-ADDR", "unique part", "XOR compressed" | Terminology varies by protocol; not consistently defined upfront |
| **Trigger/Watchpoint ID** | "triggerID", "TRIG_ID", "trigger ID" | Inconsistent capitalization and formatting across documents |

**Recommendation**: 
- Create a "Terms & Definitions" table in intro.adoc listing all key abbreviations with single authoritative term
- Use systematic naming: pick one canonical form and enforce throughout (e.g., always "TRIG_ID" when referring to register fields, "trigger ID" in prose)

### 2.2 Register Naming Conventions

**Problem**: Register field naming varies inconsistently:
- Some use prefixes: `trTeControl.*`, `trTeEvtControl.*`, `trTsControl.*`
- Inconsistent capitalization: `trTeFilterEnable` vs. `trTeEvtFeatures`
- Field name notation: Sometimes `trTeControl.trTeInstMode`, sometimes just `trTeInstMode`

**Current State** (from Trace-Control-Extensions.adoc):
```
✓ Clear: "trTeControl.trTeInstMode = 4"
✗ Unclear: Mixed usage of fully-qualified vs. abbreviated register names
```

**Recommendation**: Establish a register naming style guide:
- Always use fully-qualified names in specifications: `trTeControl.trTeInstMode`
- Define abbreviation rules for tables where space is limited
- Create a reference table mapping all `trTe*` prefixes to their subsystems

### 2.3 Cross-File Reference Consistency

**Current Issues**:

1. **Inconsistent anchor syntax**:
   - `[[body]]` in body.adoc
   - `[#intro]` in intro.adoc
   - `[#Debug-Trigger-Watchpoint-Events]` in body.adoc
   
2. **Missing cross-file references**:
   - body.adoc defines Event Types but doesn't link to corresponding message formats in N-Trace-packets.adoc
   - N-Trace-packets.adoc references Event Types but doesn't link back to body.adoc for detailed feature descriptions
   
3. **Inconsistent citation format**:
   - Some use: `cite:[riscv-RHTI-spec-0.83]`
   - Some use: `cite:[riscv-RHTI-spec-0.83(Chapter name)]`
   - Some use: `cite:[riscv-RHTI-spec-0.83(Optional sideband signals)]`

**Recommendation**:
- Standardize on consistent anchor format (recommend `[#section-name]` format)
- Add cross-reference table at end of intro.adoc mapping Events → Message Formats → Registers
- Create explicit "See also" sections in related subsections
- Standardize citation format: always include chapter/section reference for clarity

---

## 3. READABILITY ANALYSIS

### 3.1 Document Length & Complexity

| Document | Primary Purpose | Approx. Complexity | Issue |
|-----------|-----------------|-------------------|-------|
| **intro.adoc** | Introduction | Low | Too brief; minimal context; missing overview diagram |
| **body.adoc** | Core features & types | **HIGH** | Extremely dense (~500 lines); multiple interwoven concepts |
| **RHTI-Extensions.adoc** | Hardware signals | Medium | Very brief (~60 lines); could benefit from more explanation |
| **Trace-Control-Extensions.adoc** | Register definitions | Low-Medium | Sparse content; abruptly ends; feels incomplete |
| **N-Trace-packets.adoc** | Message formats | **HIGH** | Dense technical tables; limited explanatory text |
| **E-Trace-packets.adoc** | Packet formats | Medium | Incomplete (Format 0 listed as "Plan to define") |
| **appendix.adoc** | Operations & Filters | Medium | Good organization but key concepts should be earlier |

### 3.2 Prose vs. Table Balance

**Problem**: Heavy reliance on tables for content delivery:

- **N-Trace-packets.adoc**: ~70% tables, 30% prose
- **body.adoc**: ~40% tables, 60% prose (better balanced)
- **Trace-Control-Extensions.adoc**: ~60% tables, 40% prose

**Impact**: 
- Tables are efficient but lack explanatory context
- Readers often need to cross-reference multiple tables to understand a single concept
- Dense technical tables without introduction paragraph are difficult to parse

**Example (from N-Trace-packets.adoc)**:
```asciidoc
.Event Trace non-Function Messages, per-EVTSRC (type)
[cols="12%,10%,10%,10%,11%,~", options="header"]
|===
^|Event ^|EVTSRC ^|EVTCNT ^|EVTPC ^|EVTDATA ^|Description
...
|===
```
✗ **Issue**: No introductory paragraph explaining what EVTSRC values mean or why readers should care  
✓ **Fix**: Add: "The following table details the Event Source (EVTSRC) encoding for non-function trace events. Each EVTSRC value represents a specific event type; see [Event Types](#event-types) for feature descriptions."

### 3.3 Terminology Clarity

**Current Problems**:

1. **Implicit assumptions**: Many documents assume readers understand RISC-V core concepts without introduction
   - Example: "xepc", "xcause" are used without definition
   - Example: N-Trace, E-Trace protocol differences assumed knowledge
   
2. **Acronym overload**:
   - EVTSRC, EVTPC, EVTDATA, EVTCNT (introduced together)
   - TCODE, TDATA, TSTAMP, SRC, CDF (mixed in different sections)
   - PPC, HPM, mhpmEventX (performance counter concepts conflated)

3. **Implicit cross-references**: 
   - body.adoc references RHTI spec but doesn't explain what RHTI is
   - E-Trace-packets.adoc references "Format 1", "Format 2", "Format 3" without explaining that these are standard E-Trace formats

**Recommendation**:
- Add "Definitions" subsection in intro.adoc with all key terms
- Define acronyms inline on first use: "Event Source (EVTSRC)"
- Use consistent notation: When introducing register fields, use uniform syntax:
  ```
  Field Name (abbreviated notation): Description
  Example: Source Hart (SRC): [width] bits
  ```

### 3.4 Navigation Aids

**Current State**:
- No table of contents structure emphasized in analysis
- Section numbering exists but hierarchy unclear
- Cross-document navigation requires manual lookup

**Missing Elements**:
- No "Quick Start" guide for implementers
- No flowchart showing event lifecycle (trigger → tracing → message output)
- No decision tree for register configuration
- No index section linking topics across documents

**Recommendation**: 
- Add visual flowcharts in intro.adoc showing:
  1. Event Types → N-Trace Messages mapping
  2. Event Types → E-Trace Packets mapping
  3. Configuration flow (which registers enable which events)
- Create hyperlinked reference tables at document boundaries

---

## 4. STRUCTURE & ORGANIZATION

### 4.1 Section Hierarchy Issues

**Problem**: Inconsistent nesting depth within and across documents.

**Example - body.adoc section hierarchy**:
```
== Features                          (Level 2)
=== Event Types                      (Level 3)
.List of Event Trace Event Types    (Table title, not section)
|Exceptions                         (Table cell, not section)
```

**Issue**: Important event type descriptions are buried in table cells, making them unfindable via TOC.

**Example - Trace-Control-Extensions.adoc**:
```
== Trace Control Extensions for Event Trace    (Level 2)
=== Event Trace Control Register Mode Field    (Level 3)
=== Time Synchronization Message              (Level 3)
==== Register *trTsControl*:...              (Level 4)
```

**Issue**: Abrupt jump from Level 3 to Level 4; inconsistent hierarchy complicates auto-generated TOC.

**Recommendation**:
- Standardize on consistent hierarchy:
  - Level 1: Document title (only in master document)
  - Level 2: Major sections (Features, Message Formats, Registers, etc.)
  - Level 3: Subsections (Event Types, Call/Return packets, etc.)
  - Level 4: Details (specific register fields, message variants)
  - Limit to 4 levels maximum
- Move table cell descriptions to section text

### 4.2 Content Fragmentation

**Problem**: Related information scattered across multiple files:

| Concept | Defined In | Referenced In | Issue |
|---------|-----------|---------------|-------|
| **Function Calls** | body.adoc (section "Event Types") | N-Trace-packets.adoc (message definitions) | Two separate definitions; inconsistent detail level |
| **External Signal Events** | body.adoc (with waveforms) | N-Trace-packets.adoc (brief table entry) | Detailed feature description in body; implementation details in N-Trace |
| **Periodic PC Sampling** | body.adoc (with waveforms) | N-Trace-packets.adoc (table entry only) | Asymmetric coverage |
| **Profiling Performance Counters** | body.adoc (extensive) | N-Trace-packets.adoc (message only) | Control registers missing from Trace-Control-Extensions.adoc |

**Impact**: 
- Readers cannot understand full feature implementation without consulting multiple files
- Difficult to verify completeness of specifications

**Recommendation**: 
- Create a unified "Event Type Implementation Guide" showing for each event type:
  1. High-level feature description
  2. Control register requirements
  3. Message/packet format(s)
  4. Example register configuration
  5. Example output message
- Maintain detailed sections in separate files but provide cross-reference matrix

### 4.3 Body.adoc Complexity

**Problem**: body.adoc is 500+ lines covering multiple disparate topics:

1. **Features** (bullet list, ~25 items)
2. **Event Types** (table + brief descriptions)
3. **Detailed event type descriptions** (mixed with implementation details)
4. **Block diagram**
5. **Features and Benefits** (tabular)
6. **Non-function Events** (detailed message field table)
7. **Function Events** (message type table)
8. **Context + Privilege Level Events** (mixing concepts)
9. **Debug Trigger/Watchpoint Events** (dense paragraph)
10. **User Definable Signal Events** (minimal detail)
11. **Periodic PC Sampling Events** (detailed with references)
12. **External Signal Events** (extensive with waveforms)
13. **Performance Counter Events** (extensive with waveforms)
14. **Bandwidth limiting** (brief paragraph)
15. **Profiling Performance Counters** (extensive with alternatives and tables)

**Root Cause**: No clear distinction between "what Event Trace can do" vs. "how to implement each feature"

**Recommendation**: Reorganize body.adoc:
```
== Overview of Event Trace
=== Capabilities (current "Features")
=== Architecture (block diagram + context)

== Event Types Reference
=== [Brief definition of each event type]

== Feature Implementations (moved to separate section or new document)
=== Non-Function Events
=== Function Events (Calls/Returns)
=== Performance Monitoring
=== (etc.)
```

---

## 5. CLARITY & COMPREHENSIBILITY

### 5.1 Assumptions About Reader Knowledge

**High assumptions** (without introduction in main spec):
- Understanding of RISC-V privilege modes (U, S, HS, M, VS, VU, D)
- Knowledge of RISC-V Hart Trace Interface (RHTI) concept
- Familiarity with N-Trace and E-Trace protocols
- Understanding of hardware trigger modules (Sdtrig)
- Knowledge of performance counter selectors (mhpmEventX)

**Problem**: Readers unfamiliar with RISC-V ecosystem will struggle.

**Recommendation**:
- Add "Prerequisite Knowledge" section in intro.adoc
- Add brief "RISC-V Trace Ecosystem" context explaining RHTI, N-Trace, E-Trace relationships
- Link to official RISC-V documentation for foundational concepts

### 5.2 Example Completeness

**Found in spec**:
- ✓ Waveform diagrams (External Signal Events, Performance Counter Selectors) - excellent
- ✗ Register value examples: Tables show field definitions but lack realistic configuration examples
- ✗ Message/packet examples: Format descriptions without sample bit patterns or annotated hexadecimal examples
- ✗ Configuration examples: No worked example showing "how to configure Event Trace to trace function calls"

**Recommendation**: Add "Configuration Examples" section showing:
```asciidoc
.Example: Tracing Function Calls
1. Set trTeControl.trTeInstMode = 4 (Event Trace mode)
2. Set trTeControl.trTeEvtCallReturnEn = 1 (enable call/return events)
3. Set trTeControl.trTeEvtCallReturnCnt = 0b111 (select all call/return variants)
4. Set timestamp control registers...
5. Expected message output: CALL_SYNC (0x24), CALL_FULL (0x25), RET_FULL (0x28)
```

### 5.3 Clarity Issues in Key Sections

#### Section: "Profiling Performance Counters (PPCs)" in body.adoc
**Issue**: Introduces "Alternative 1" and "Alternative 2a", "Alternative 2b" but:
- Doesn't clearly state which is recommended
- Advantages/disadvantages scattered across long paragraphs
- Decision criteria unclear
- Alternative sections don't have clear titles (use `[#Alternative-1]` anchors but no descriptive headers)

**Current structure**:
```
=== Alternative 1: Delta counters based on hpmcounters
1) disadvantage...
2) disadvantage...
3) disadvantage...
No additional control registers...

=== Alternative 2a: Delta counters based on independent...
...

=== Alternative 2b: Delta counters based on...
```

**Recommendation**: Use decision table:
```asciidoc
.Profiling Performance Counter Implementation Options
[cols="25%,35%,40%", options="header"]
|===
| Approach | Advantages | Disadvantages | Recommended For

| Alt 1: HPMCounter-based
| Reuses existing hardware
| Shared resource conflicts...
| Low-cost implementations without PPC-specific needs

| Alt 2a: Independent + own selectors
| Complete independence from HPM...
| Additional control registers...
| High-performance systems requiring detailed profiling

| Alt 2b: Independent + HPM selectors
| Hybrid approach...
| Complexity...
| (Unclear - more justification needed)
|===
```

#### Section: "RHTI-Extensions.adoc"
**Issue**: Very brief (~60 lines total); contains critical signal definitions but minimal explanation

**Current structure**:
```
=== Trigger/Watchpoint ID
This file... defines extensions to... [reference]
The RHTI defines trigger [2] signal...
Following signal definition extends... Table 8...
.Optional sideband encoder triggerID output signals
[table]
```

**Problem**: 
- No explanation of WHY triggerID is needed
- Context about RHTI trace-notify signal assumed knowledge
- Only one informational note; limited clarification
- Feels like a fragment rather than a complete section

**Recommendation**: Expand with:
```asciidoc
=== Trigger/Watchpoint ID
==== Overview
When debug triggers/watchpoints match during Event Trace execution...
[explain why trace needs to know which trigger fired]

==== RHTI Integration
The RISC-V Hart Trace Interface (RHTI) specification defines...
[explain RHTI trace-notify signal, how it relates to Event Trace]

==== Signal Definition
[existing table]

==== Implementation Notes
...
```

#### Section: "Interrupt Cause Register Filtering" in appendix.adoc
**Issue**: Confusing NOTE about field name mismatch

**Current text**:
```
NOTE: Several of the registers are "shared" with Ecause filtering, thus the name of 
the field may not match the comparison being made. For example, trTeFilterMatchChoiceEcauseLow 
is used to compare...
```

**Problem**: 
- Requires readers to understand WHY names don't match (shared hardware)
- Confusing for implementers to track which fields apply to interrupts vs. exceptions
- Could lead to incorrect register configuration

**Recommendation**:
```asciidoc
NOTE: Interrupt and Exception Cause Filtering share register fields to reduce hardware 
complexity. The field names reference "Ecause" (Exception Cause) but are reused for 
Interrupt Cause filtering as well. When configuring Interrupt Cause filtering, use the 
"Ecause" fields as documented below, paying attention to the EVTSRC value (3 for 
interrupts, not exceptions).
```

---

## 6. FORMAT & PRESENTATION CONSISTENCY

### 6.1 Table Formatting

**Issues**:
- Table column widths specified inconsistently:
  ```
  [cols="25%,30%,~"]      ← Some files
  [cols="12%,10%,12%,10%,~"]  ← Others
  [cols="35%, 10%, ~"]    ← Mixed spacing around comma
  ```
  
- Table captions placement inconsistent:
  ```
  .Table Title
  [options="header"]
  |===           ← Some
  
  vs.
  
  .Table Title
  [cols="..."]
  |===           ← Others
  ```

- Header row handling:
  ```
  ^|Field name   ^|Value   ← Centering sometimes
  |Field name |Value       ← Not centering other times
  ```

**Recommendation**: Create style guide and enforce:
```asciidoc
// Standard table format:
.Table Title
[cols="20%,40%,40%", options="header"]
|===
^|Column A ^|Column B ^|Column C
|row 1a |row 1b |row 1c
|===
```

### 6.2 Code/Literal Text Formatting

**Inconsistencies**:
- Register names: Sometimes `trTeControl.field`, sometimes `*trTeControl.field*`, sometimes `*trTeControl*.field`
- Message type names: Sometimes `CALL_SYNC`, sometimes `*CALL_SYNC*`, sometimes bold followed by italics
- Field values: Sometimes `0x22`, sometimes `0x22 (34)`, sometimes both in different sections
- Acronyms: Sometimes `EVTSRC`, sometimes `*EVTSRC*`

**Current example** (from N-Trace-packets.adoc):
```
|*CALL_SYNC*
|36
|PC +
(F-ADDR)
|Dest Addr +
(U-ADDR)

vs. later:

|CALL_SYNC Function Event
|36
|PC +
(F-ADDR)
```

**Recommendation**: Establish formatting rules:
- Register fully-qualified paths always in code: `` `trTeControl.trTeInstMode` ``
- Message/packet types: **CALL_SYNC** (bold, always uppercase)
- Hex values: always include decimal equivalent: `0x22 (34)`
- Acronyms: define on first use, then use consistently

### 6.3 Note/Warning/Important Emphasis

**Current usage**:
- `NOTE:` used inconsistently (some have boxes, some don't)
- `WARNING:` appears in preamble only
- No consistent "IMPORTANT" callout
- Some implementation notes use prose, others use NOTE blocks

**Example inconsistencies**:
```
// From N-Trace-packets.adoc:
NOTE: the N-trace specification uses the term "message"...

// vs. appendix.adoc:
NOTE: Several of the registers are "shared"...

// Some have block formatting, some inline
```

**Recommendation**: 
- Use consistent admonition formatting for all callouts
- Define three categories:
  - `NOTE:` for informational clarifications
  - `IMPORTANT:` for normative requirements
  - `WARNING:` for potential pitfalls

---

## 7. SPECIFIC PROBLEM AREAS

### 7.1 Missing or Incomplete Sections

| Section | Status | Issue |
|---------|--------|-------|
| **Control Register Specifications** | Incomplete | Trace-Control-Extensions.adoc ends abruptly; many registers referenced but not defined |
| **Event Filtering Details** | Partial | Appendix covers some filters; many referenced in body.adoc without register definitions |
| **Bandwidth Management** | Brief mention | Section "Limiting Event Trace Bandwidth..." is 5 lines; sparse |
| **Error Handling** | Missing | No guidance on error states, recovery, buffer overflow behavior |
| **Performance/Latency** | Missing | No discussion of timing, when messages are generated, latency guarantees |
| **E-Trace Format 0** | Explicitly incomplete | "Plan to define for Performance counters" - status unclear |
| **Multi-Hart Coordination** | Minimal | SRC field defined but no explanation of multi-hart scenarios, synchronization |

### 7.2 E-Trace-packets.adoc Issues

**Critical Issue**: File appears incomplete and somewhat orphaned

1. **Section titled "Format 3 subformat 1 - Trap"** (line 52):
   ```
   === Format 3 subformat 1 - Trap
   This packet is identical to the instruction trace sync-trap packet...
   ```
   
   **Problem**: 
   - States it's "identical to instruction trace" but doesn't explain what that is
   - No table or detailed field definitions
   - References external spec without summary
   
2. **Duplicated anchor** (lines 23, 51):
   ```
   [[sec:format30]]  ← appears twice
   ```
   This will cause documentation build failures.

3. **Premature document ending** (line 121):
   ```
   === Format 0 packets
   Plan to define for Performance counters.
   ```
   This suggests the document is draft/incomplete.

**Recommendations**:
- Fix duplicate anchor `[[sec:format30]]` (should be sec:format31 for trap section)
- Complete Format 0 packet definition for performance counters
- Add introductory section explaining E-Trace vs. N-Trace packet philosophy
- Provide comparison table: same message type, N-Trace format vs. E-Trace format

### 7.3 Trace-Control-Extensions.adoc Content Gap

**Current content**:
- Title promises extensions to Trace-Control Interface
- Only defines one register field: `trTsControl.TrTsSyncMax`
- Defines MTIME_SYNC behavior
- Document ends abruptly (line 51)

**Missing**: Complete register definitions for:
- `trTeControl.*` fields (mentioned in body.adoc but only one field defined in this file)
- `trTeEvtControl.*` fields
- `trTeEvtFeatures.*` fields
- All filter control registers
- Performance counter control registers

**Recommendation**: Either:
1. Complete this document with comprehensive register definitions, OR
2. Move to appendix and reduce expectations, OR
3. Create separate "Register Reference" document with all register definitions in one place

---

## 8. READABILITY IMPROVEMENTS

### 8.1 Missing Context Connectors

**Problem**: Sections don't clearly explain how they relate to preceding content

**Example** (from body.adoc):
```
=== Periodic PC Sampling Events

For the *Periodic PC sampling* event type, a programmable counter runs until it 
expires which generates a trace packet...
```

**Missing**: 
- Why is this useful? (context should reference Features table)
- How does this relate to Performance Counter Selector Events? (both timer-based)
- When would you use this vs. external signal sampling?

**Recommendation**: Add transition sentences:
```asciidoc
=== Periodic PC Sampling Events
*Usefulness*: As described in the Features section [link], Periodic PC Sampling 
provides statistical profiling with no performance overhead. Unlike Performance Counter 
Selectors (see <<Performance-Counter-Selector-Based-Events>>) which track specific 
microarchitectural events, PC Sampling captures the execution address at fixed intervals.

*When to use*: Choose Periodic PC Sampling when you need...
```

### 8.2 Visual Hierarchy Improvements

**Current state**: Heavy text-only presentation with scattered tables

**Recommendation**: Add more visual elements:
- Flow diagrams showing event lifecycle
- State machines for trace control
- Register field diagrams (bit-level visual representation)
- Packet structure diagrams (visual hex layout)
- Feature matrix table (events × capabilities)

**Example to add**:
```asciidoc
.Event Lifecycle Diagram
....
[Event Trigger] → [Hardware Detection] → [Message/Packet Encoding] → [Trace Output]
     ↓                    ↓                           ↓                    ↓
(varies by event)  (enable control)       (format selection)        (N-Trace or E-Trace)
....
```

### 8.3 Argument Clarity in Complex Sections

**Problem**: Dense paragraphs without clear argument structure

**Example** (Performance Counter Selector Events):
```
RISC-V programmable performance counters have selector registers (programmed with the 
mhpmeventX CSRs) which are user-programmed to select one (or several) event types to 
count. (The 'X' of mhpm...

*Single event vs. multi-cycle duration.* Some counter event types are singular events...
Some event types are...

*Level-to-Edge Conversion.* This document assumed selector outputs are level sensitive...

*Stretching logic high levels.* Event Trace hardware must accommodate signals...
```

**Problem**: Each subtopic introduced with bold label but no clear framing of argument

**Recommendation**: Use structured list:
```asciidoc
=== Performance Counter Selector-Based Events

Event Trace can leverage RISC-V programmable performance counters (mhpmeventX CSRs) to 
generate trace packets when selected events occur.

==== Key Considerations for Implementation

*Event Duration Classification*: Performance counter selectors output two types of signals:
- Singular: true for one cycle (e.g., instruction retired)
- Multi-cycle: level persists across multiple cycles (e.g., cache miss active)

Recommendation: [guidance]

*Level-to-Edge Conversion*: Selector outputs are level-sensitive but Event Trace requires...
Solution: [description]

*Stretching High Levels*: When trace clock is slower than CPU clock...
Requirement: [description]
```

---

## 9. CROSS-DOCUMENT LINKAGE

### 9.1 Navigation Between Files

**Current state**:
- Documents reference each other sparingly
- No explicit "next section" or "previously discussed" connectors
- Readers often don't know related sections exist in other files

**Missing linkages**:

| Topic | Location 1 | Location 2 | Missing Link |
|-------|-----------|-----------|-------------|
| Event Types | body.adoc | N-Trace-packets.adoc | No explicit mapping |
| Trigger/Watchpoint | body.adoc | RHTI-Extensions.adoc | Casual mention only |
| Context/Priv Level | body.adoc | appendix.adoc | Mentioned in both, no coordination |
| External Signals | body.adoc (detailed) | N-Trace-packets.adoc (brief) | Different levels of detail, no pointer |
| Profiling Counters | body.adoc (extensive) | Trace-Control-Extensions.adoc (missing) | No register definitions for controls |

**Recommendation**: Create "Cross-Reference Matrix" document:
```asciidoc
[#xref-matrix]
== Event Type to Implementation Mapping

.Function Calls and Returns
|===
| Aspect | Location | Details
| Feature Description | body.adoc § Event Types → Function Calls, Returns | [link]
| Register Controls | Trace-Control-Extensions.adoc § [TBD] | [link]
| N-Trace Formats | N-Trace-packets.adoc § Call/Return Messages | [link]
| E-Trace Formats | E-Trace-packets.adoc § Format 1 | [link]
| Configuration Example | appendix.adoc § [TBD] | [link]
|===
```

### 9.2 Indexing

**Current state**: No index provided; no cross-reference consolidation

**Impact**: Readers must remember which document contains specific information

**Recommendation**: Generate comprehensive index with entries like:
```
CALL_SYNC → see N-Trace-packets.adoc § CALL_SYNC Function Event (page X)
Filtering → see appendix.adoc § Event Trace Global and per-Event Filtering (page X)
trTeControl.trTeInstMode → see Trace-Control-Extensions.adoc § [register definition] (page X)
Trigger ID → see RHTI-Extensions.adoc § Trigger/Watchpoint ID (page X)
```

---

## 10. RECOMMENDATIONS SUMMARY

### Priority 1: Critical Clarity Issues (Implement First)

1. **Create Unified Glossary** (affects all files)
   - Define all abbreviations, acronyms, and terminology
   - Establish canonical forms (e.g., always "TRIG_ID" vs. "Trigger ID")
   - Add at beginning of intro.adoc

2. **Fix Duplicated Anchor in E-Trace-packets.adoc** (line 51)
   - Change `[[sec:format30]]` → `[[sec:format31]]` for trap section
   - Prevents documentation build failures

3. **Complete E-Trace-packets.adoc**
   - Define Format 0 packets for performance counters
   - Add explanatory introduction to E-Trace philosophy

4. **Add Event Type to Message/Packet Mapping**
   - Create table in intro.adoc or appendix showing:
     - Each event type
     - Control registers required
     - N-Trace message format(s)
     - E-Trace packet format(s)

### Priority 2: Structure & Organization (Implement Second)

5. **Reorganize body.adoc**
   - Split into two sections: Overview/Features vs. Detailed Implementation
   - Move profiling counter alternatives to separate section or new document
   - Standardize section hierarchy (avoid mixing levels 3 & 4)

6. **Complete Trace-Control-Extensions.adoc**
   - Define all Event Trace control registers
   - Reference register definitions in tables showing:
     - Register address
     - Field name
     - Bit range
     - Field description
     - Read/Write access
     - Reset value

7. **Create Terminology Consistency**
   - Standardize register field naming conventions
   - Use consistent citation format across all files
   - Define abbreviation usage rules

8. **Add Cross-Reference Matrix**
   - Map features to implementing documents
   - Provide links between related sections

### Priority 3: Readability Enhancements (Implement Third)

9. **Add Worked Examples**
   - Configuration examples (register setup scenarios)
   - Message/packet examples (hex annotations)
   - Trace output examples (how results appear)

10. **Improve Prose Structure**
    - Add context connectors between sections
    - Use structured lists for complex topics (not just bold labels)
    - Provide "why" not just "how"
    - Add decision trees for configuration choices

11. **Create Visual Aids**
    - Event lifecycle flowchart
    - Trace configuration state machine
    - Feature capability matrix
    - Register field visual diagrams

12. **Add Implementation Notes**
    - Guideline on which alternatives are recommended
    - Known implementation challenges
    - Performance considerations

### Priority 4: Polish (Implement Last)

13. **Standardize Formatting**
    - Table formatting (column widths, headers)
    - Code/literal text formatting (registers, messages)
    - Admonition usage (NOTE, IMPORTANT, WARNING)
    - Emphasis (bold, italic, monospace)

14. **Create Index**
    - Alphabetical index of all concepts
    - Cross-references with document location

15. **Generate Quick Reference**
    - One-page summary of key information
    - Register quick lookup
    - Message format quick reference

---

## 11. RECOMMENDATIONS BY DOCUMENT

### intro.adoc
- [ ] Add prerequisites/assumed knowledge section
- [ ] Add glossary of all abbreviations and terminology
- [ ] Add overview diagram of trace architecture
- [ ] Add "Quick Reference" or feature matrix table
- [ ] Expand brief feature introduction

### body.adoc
- [ ] Split Features from Feature Implementations
- [ ] Standardize section hierarchy
- [ ] Move Event Type descriptions from table cells to proper sections
- [ ] Add decision trees for configuration choices
- [ ] Connect to register definitions in other documents

### RHTI-Extensions.adoc
- [ ] Expand "Overview" section explaining RHTI role
- [ ] Add implementation notes
- [ ] Clarify relationship to Event Trace vs. Instruction Trace

### Trace-Control-Extensions.adoc
- [ ] Complete with all control register definitions
- [ ] Add register layout diagrams
- [ ] Add configuration examples
- [ ] Clarify which registers are optional vs. required

### N-Trace-packets.adoc
- [ ] Add introductory section explaining message framing
- [ ] Add context paragraph before each message type table
- [ ] Provide example bit patterns or hex sequences
- [ ] Add cross-links to feature descriptions in body.adoc

### E-Trace-packets.adoc
- [ ] Complete Format 0 definition
- [ ] Fix duplicate anchor
- [ ] Add E-Trace vs. N-Trace comparison
- [ ] Add example packet layouts
- [ ] Add cross-links to message definitions

### appendix.adoc
- [ ] Keep non-normative content clear
- [ ] Move key operations to normative section (if not already)
- [ ] Expand Filtering section with configuration examples
- [ ] Add more operational guidance

---

## 12. METRICS & SUCCESS CRITERIA

After implementing recommendations, the specification should achieve:

| Metric | Current | Target |
|--------|---------|--------|
| **Terminology Consistency** | Many inconsistencies | 100% adherence to glossary |
| **Cross-file navigation** | Minimal/manual | Every reference linked; matrix provided |
| **Worked examples** | Few/incomplete | One per major feature |
| **Section hierarchy consistency** | Levels 2-4 mixed | Always levels 2-3-4 max |
| **Table formatting** | Inconsistent | Uniform standard applied |
| **Acronym definition** | First use sometimes omitted | All defined on first use |
| **Reader comprehension** | Assumed knowledge significant | Clear prerequisites in intro |
| **Configuration guidance** | Scattered | Centralized with decision trees |

---

## CONCLUSION

The RISC-V Event Trace specification is technically comprehensive but suffers from **inconsistent presentation, fragmented organization, and navigational challenges**. The core technical content is sound, but readers—especially those implementing Event Trace hardware or tools—will struggle with:

1. **Finding complete information** about any single topic (scattered across documents)
2. **Understanding relationships** between concepts (missing cross-references)
3. **Choosing implementations** (alternatives presented without clear guidance)
4. **Configuring hardware** (control registers not centralized; examples absent)

**Key Wins**: The specification includes excellent visual aids (waveform diagrams), detailed feature descriptions, and multiple implementation options.

**Key Gaps**: No glossary, incomplete control register documentation, fragmented feature-to-implementation mapping, minimal worked examples, and inconsistent terminology/formatting across documents.

By implementing the Priority 1 recommendations (glossary, mapping tables, document fixes) and Priority 2 recommendations (reorganization, completeness), the specification will become significantly more accessible and usable for implementers and tool developers.

---

**Report Date**: August 20, 2026  
**Analysis Scope**: intro.adoc, body.adoc, RHTI-Extensions.adoc, Trace-Control-Extensions.adoc, N-Trace-packets.adoc, E-Trace-packets.adoc, appendix.adoc

