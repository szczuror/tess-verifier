# Use Case Specification: TESS Verifier

Based on the project documentation (`vision.md`, `requirements.md`, `glossary.md`), this document details the primary Use Cases for the **TESS Verifier** system.

---

## UC-01: Verify Planet Candidate Signal (TOI)

### 1. Brief Description
This is the core use case of the system. A user inputs a candidate's TESS Object of Interest (TOI) number (e.g., `TOI-1234.01`). The system automatically queries external exoplanet catalogs and scientific paper archives, selects and executes applicable verification tests (such as checking for eclipsing binary star signals or centroid shifts caused by nearby blending stars), and produces a structured report containing a single verdict, a one-sentence rationale, test details, and external source links. The user can then ask follow-up questions regarding the report.

### 2. Actors
* **Primary Actor:** User (student, amateur astronomer, researcher).
* **Secondary Actors / External Systems:**
  * Exoplanet Catalogs (e.g., NASA Exoplanet Archive)
  * Scientific Paper Databases (e.g., arXiv / ADS)
  * Star Catalogs & Light Curve Repositories (e.g., TESS Input Catalog - TIC, MAST Archive)

### 3. Preconditions
* The system is active and connected to the internet.
* The target candidate has an assigned TOI identifier and catalog entries for transit timing and depth.

### 4. Postconditions
* **Success:**
  * A structured verification report is generated and presented to the user.
  * The report contains exactly one verdict (*likely planet*, *false positive*, or *inconclusive*), a one-sentence rationale, test execution logs, and external reference links.
  * The system enters a interactive state capable of answering follow-up questions regarding the generated report.
* **Failure:**
  * The system alerts the user if the candidate ID is invalid or required data cannot be found.

### 5. Main Success Scenario (MSS)

| Step | Actor | Action / System Response | Related Req. |
| :--- | :--- | :--- | :--- |
| **1** | User | Enters a candidate's TOI identifier (e.g., `TOI-1234.01`). | FR-01 |
| **2** | System | Fetches the current catalog status (e.g., *planet candidate*, *confirmed planet*, *false positive*) with source and date from external exoplanet databases. | FR-03, FR-14 |
| **3** | System | Searches scientific literature for papers mentioning the candidate, retrieving titles, publication years, and classifying whether each paper confirms, rules out, or mentions the candidate. | FR-04, FR-05, FR-14 |
| **4** | System | Evaluates target star surroundings to identify any neighboring stars within 2.5 arcminutes bright enough to produce the observed dip if eclipsed. | FR-07 |
| **5** | System | Selects and runs applicable verification tests (e.g., checking for eclipsing binary features or centroid offsets). | FR-06, FR-07 |
| **6** | System | Assigns an outcome status to each executed test: *passed* (fits a planet), *failed* (points to false positive), or *unclear*. | FR-09 |
| **7** | System | Evaluates test outcomes to derive a single verdict: <br>• ***false positive*** if any test failed,<br>• ***likely planet*** if every required test passed,<br>• ***inconclusive*** otherwise. | FR-10, FR-11 |
| **8** | System | Compiles and displays the report detailing:<br>• Candidate catalog disposition & literature summary,<br>• Executed and skipped tests with explicit reasons for each,<br>• Final verdict with a single-sentence primary rationale,<br>• External reference links for all cited facts. | FR-08, FR-10, FR-12, FR-13, FR-14 |
| **9** | User | Asks a follow-up question regarding the report (e.g., asking why a specific test was skipped or what a result implies). | FR-15 |
| **10** | System | Answers the follow-up question in context. | FR-15 |

---

### 6. Alternate & Exception Flows

#### 2a. Candidate or Data Not Found
* **2a1.** The system cannot locate the candidate or essential light curve/catalog data in external sources.
* **2a2.** The system notifies the user specifically which data or identifier is missing and terminates the process. (FR-02)

#### 4a. Bright Nearby Star Detected (Blending Check Triggered)
* **4a1.** The system identifies a star within 2.5 arcminutes capable of producing the dip depth.
* **4a2.** System prioritizes and runs a centroid/pixel-level test to determine which star host the signal before evaluating other tests. (FR-07)

#### 7a. Verdict Determination - False Positive
* **7a1.** At least one verification test fails (e.g., Eclipsing Binary signal or signal originating from nearby star).
* **7a2.** System sets verdict to *false positive* and explicitly names the likely cause (e.g., "Two eclipsing stars" or "Blend from a nearby star"). (FR-11, FR-12)

#### 7b. Verdict Determination - Inconclusive
* **7b1.** Not all required tests could be conclusively passed or failed due to incomplete or noisy data.
* **7b2.** System sets verdict to *inconclusive* and explicitly states what observation or data would settle it (e.g., ground-based high-resolution imaging). (FR-11, FR-13)

---

## UC-02: Batch Candidate Verification (Optional / Could Have)

### 1. Brief Description
The user provides a list of multiple TOI candidate numbers at once. The system evaluates each candidate and presents a consolidated summary table.

### 2. Primary Actor
User

### 3. Flow of Events
1. **User** inputs a list of TOI numbers (e.g., `TOI-1234.01`, `TOI-5678.01`).
2. **System** performs verification checks (UC-01 steps 2–7) for each candidate.
3. **System** displays a summary table listing each candidate's verdict and main rationale.
4. **System** sorts the list to display *likely planet* candidates first. (FR-16)

---

## Non-Functional Requirements & Performance Goals

* **Accuracy Target (NFR-01):** On a benchmark set of $\ge 50$ resolved candidates (50% confirmed planets, 50% false positives with identifiers hidden), the system must correctly assign *likely planet* to $\ge 70\%$ of true planets and *false positive* to $\ge 70\%$ of true false positives.
* **Citation Accuracy (NFR-02):** 100% of cited papers in sample reports must exist and directly mention the candidate.
* **Consistency (NFR-03):** 9 out of 10 repeated runs on unchanged data must yield identical verdicts.
* **Execution Speed (NFR-04):** Single candidate evaluation finishes within 10 minutes for $\ge 90\%$ of candidates.
* **Usability (NFR-05):** Reports must be clear enough that at least 4 out of 5 non-expert students can correctly state the verdict and primary reason after reading.